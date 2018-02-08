# To nie (tylko) test

Czy rolą testu jest tylko "weryfikacja" lub "sprawdzenie", że oprogramowanie działa? Z pewnością jest to istotna część jego `runtime value`, tj. wartości, którą otrzymujemy kiedy uruchamiamy test. Jednakże, gdy ograniczymy naszą perspektywę jedynie do testów, możemy dojść do wniosku, że jedyną cenną rzeczą w przeprowadzaniu testu jest możliwość jego wykonania i wyświetlenia wyniku. Takie działania, jak projektowanie lub implementowanie testu, miałyby tylko wartość w stworzeniu czegoś, co można uruchomić. Czytelność testu miałaby wartość tylko podczas debugowania. Czy tak jest w istocie?

W tym rozdziale będę przekonywał, że czynności polegające na projektowaniu, implementowaniu, kompilowaniu i czytaniu testu są bardzo ważnymi działaniami. I pozwalają traktować testy jako coś więcej niż tylko "automatyczną kontrolę".

## Kiedy test staje się czymś więcej 

Studiowałem w Łodzi, dużym mieście w centrum Polski. Jak (zapewne) wszyscy inni studenci we wszystkich krajach świata, mieliśmy wykłady, ćwiczenia i egzaminy. Egzaminy były dość trudne. Ponieważ moja grupa informatyczna była na wydziale elektroniki i elektrotechniki, musieliśmy zrozumieć wiele przedmiotów, które nie miały nic wspólnego z programowaniem. Na przykład: elektrotechnika, fizyka ciała stałego lub metrologia elektryczna i elektroniczna.

Wiedząc, że egzaminy były trudne i że trudno było nauczyć się wszystkiego w trakcie semestru, wykładowcy czasami dostarczali nam przykładowe egzaminy z poprzednich lat. Pytania różniły się od tych na naszych egzaminach, ale struktura i rodzaje zadawanych pytań (praktyka i teoria itp.) były podobne. Zazwyczaj otrzymywaliśmy przykładowe pytania, zanim nauka stawała się naprawdę ciężka (co zwykle miało miejsce pod koniec semestru). Zgadnij, co się wtedy działo? Jak możecie podejrzewać, nie skorzystaliśmy z testów, które otrzymaliśmy tylko po to, by "zweryfikować" lub "sprawdzić" naszą wiedzę po ukończeniu nauki. Wręcz przeciwnie - zbadanie tych testów było pierwszym krokiem naszego przygotowania. Dlaczego tak było? Jakie było zastosowanie tych testów, skoro wiedzieliśmy, że i tak nie znaliśmy większości odpowiedzi?

Chociaż myślę, że moi wykładowcy nie zgodziliby się tutaj ze mną, to mam dość zabawne spostrzeżenie, że to co robiliśmy było podobne do "Lean Software Development". Lean to filozofia gdzie kładzie się nacisk na pozbywanie się tego, co niepotrzebne (unikanie marnotrawstwa). Każda funkcjonalność lub produkt, które nie są nikomu potrzebne, są uważane za stratę, marnotrawstwo. To dlatego, że jeśli coś nie jest potrzebne teraz to nie ma najmniejszego powodu by założyć, że kiedykolwiek będzie potrzebne. Cała taka funkcjonalność lub produkt nie dodaje żadnej wartości biznesowej. Nawet jeśli kiedykolwiek *będzie* potrzebne, to - bardzo prawdopodobne - że i tak będzie wymagać pracy, aby dopasować to do potrzeb klienta. W takim przypadku, praca - która została włożona w oryginalne rozwiązanie teraz wymagające adaptacji - jest marnotrawstwem. To kosztowało, ale nie przyniosło korzyści (nie mówię o takich rzeczach jak demo dla klienta, ale gotowy, dopracowany produkt lub funkcjonalność.

Aby wyeliminować marnotrawstwo, zazwyczaj staramy się dodawać funkcjonalności których się od nas żąda, zamiast "wpychać" funkcjonalności do produktu w nadziei, że pewnego dnia staną się one przydatne. Innymi słowy, każda funkcja ma zaspokoić konkretną potrzebę. Jeśli nie, pracę uważa się za zmarnowaną, a pieniądze poszły w błoto.

Wracając do egzaminów - dlaczego podejście polegające na przejrzeniu przykładowych testów można uznać za "lean"? Załóżmy, że naszym celem jest zaliczenie egzaminu. Dlatego - wszystko co nie przybliża nas do tego celu, jest uważane za marnotrawstwo. Jeśli egzamin z przedmiotu dotyczył tylko teorii - po co było przed egzaminem zajmować się ćwiczeniami? To, jaki jest egzamin, można było uzyskać na podstawie przykładowych testów. Testy były więc swoistą specyfikacją tego, co było potrzebne do zdania egzaminu. Pozwoliło nam to uzyskać wartościowe informacje (np. wiedzę o kształcie egzaminu) na podstawie wymagań (informacji uzyskanych z realistycznych testów), zamiast zmuszać nas do przeszukiwania wszystkiego, co już istniało (tj. wczytwywać się w materiały pod kątem i ćwiczeń, i teorii).

Dlatego też testy okazały się czymś więcej, niźli tylko testami. Okazały się bardzo cenne jeszcze przed "implementacją" (tj. uczeniem się do egzaminu). Stało się tak, bo:

1. pomogły nam skupić się na tym, co było potrzebne, aby osiągnąć nasz cel
2. odciągnęły naszą uwagę od tego, co **nie** było konieczne, aby osiągnąć nasz cel

*Swoją drogą, nie muszę chyba mówić, że celem nauki nie powinno być wyłącznie zdanie egzaminu?* 

Taka właśnie była wartość testu przed nauką. Zwróć uwagę, że testy, które otrzymywaliśmy, nie były dokładnie takie same, jak te w czasie egzaminu, więc nadal musieliśmy zgadywać. Jednak rola **testu jako wyszczególnienia potrzeby, wymagania** była już widoczna. *Można powiedzieć, specyfikacji wymagań.* 

## Testy w świecie programistów

Wybrałem tę długą metaforę, aby pokazać, że napisanie "testu" jest innym sposobem określenia wymagań, potrzeb - i że myślenie w ten sposób o testach nie jest sprzeczne z intuicją. Przykład z testami z poprzednich lat, przed egzaminem, to coś wziętego z codziennego życia. Ta sama sytuacja ma miejsce w przypadku rozwoju oprogramowania. Weźmy następujący "test" i zobaczmy, jakie potrzeby on określa:


```csharp
var reporting = new ReportingFeature();
var anyPowerUser = Any.Of(Users.Admin, Users.Auditor);
Assert.True(reporting.CanBePerformedBy(anyPowerUser));
```

(W tym przykładzie użyliśmy metody `Any.Of()`, która zwraca jakąś wartość z podanej listy. Chcemy powiedzieć: "podaj mi wartość, która jest albo `Users.Admin` albo `Users.Auditor`".)

Spójrzmy na te trzy (tylko!) linie kodu i wyobraźmy sobie, że kod produkcyjny, który sprawi, że ​​test zakończy się sukcesem, jeszcze w ogóle nie istnieje. Czego możemy się nauczyć na podstawie tych trzech linii o tym, co kod produkcyjny musi zrobić? Wyliczaj ze mną:

1. Potrzebujemy czegoś, co cechuje możliwość raportowania.
2. Musimy używać pojęcia użytkowników i przywilejów.
3. Musimy używać koncepcji użytkownika zaawansowanego, który jest administratorem lub przeprowadza audyt.
4. Tak zwani `Power users`, czyli "użytkownicy mający moc", muszą mieć możliwość raportowania (pamiętaj, że nie określiliśmy, czy jacyś inni użytkownicy powinni lub nie powinni mieć możliwości korzystania z funkcji raportowania - potrzebowalibyśmy do tego osobnego "testu").

Ponadto, już jesteśmy po fazie projektowania interfejsu API (ponieważ test już używa jakiegoś API), który pasowałby do powyższych wymagań. Czy nie sądzisz, że to całkiem dużo informacji o funkcjach aplikacji - wnioskując po zaledwie trzech liniach kodu?

## Raczej specyfikacja niż zbiór testów

Mam nadzieję, że teraz widzicie, że to, co nazywaliśmy "testem", może być również postrzegane jako rodzaj specyfikacji. Jest to również odpowiedź na pytanie, które zadałem na początku tego rozdziału (o to, co jest rolą testu)

W rzeczywistości test, napisany przed kodem produkcyjnym, ma następujące funkcje :

* projektowanie scenariusza - kiedy określamy nasze wymagania, podając konkretne przykłady zachowań, których się spodziewamy
* pisanie kodu testowego - kiedy określamy interfejs API, za pomocą którego chcemy wywoływać testowany kod
* kompilowanie - gdy otrzymujemy informację od kompilatora, że kod produkcyjny ma klasy i metody wymagane przez specyfikację, którą napisaliśmy. Jeśli nie, kompilacja zakończy się niepowodzeniem. 
* wykonanie - kiedy otrzymujemy informację zwrotną od testu, czy kod produkcyjny zachowuje się tak jak opisano w specyfikacji. Jeśli nie, test powinien zakończyć się fiaskiem (failed).
*  czytanie - to miejsce, w którym wykorzystujemy już napisaną specyfikację, aby uzyskać wiedzę na temat tego, jak korzystać z kodu produkcyjnego.

Dlatego nazwa "test" to trochę za mało, by oddać co tutaj robimy. Moje odczucia są takie, że inna nazwa mogłaby być lepsza - stąd określenie *specyfikacja*.

The discovery of tests' role as a specification is quite recent and there is no uniform terminology connected to it yet. Some like to call the process of using tests as specifications *Specification By Example* to say that the tests are examples that help specify and clarify the functionality being developed. Some use the term BDD (*Behavior-Driven Development*) to emphasize that writing tests is really about analysing and describing behaviors. Also, you might encounter different names for some particular elements of this approach, for example, a "test" can be referred to as a "spec", or an "example", or a "behavior description", or a "specification statement" or "a fact about the system" (as you already saw in the chapter on tools, the xUnit.NET framework marks each "test" with a `[Fact]` attribute, suggesting that by writing it, we are stating a single fact about the developed code. By the way, xUnit.NET also allows us to state ‘theories' about our code, but let's leave this topic for another time).

Given this variety in terminology, I'd like to make a deal: to be consistent throughout this book, I will establish a naming convention, but leave you with the freedom to follow your own if you so desire. The reason for this naming convention is pedagogical -- I am not trying to create a movement to change established terms or to invent a new methodology or anything -- my hope is that by using this terminology throughout the book, you'll look at some things differently[^opensourcebook]. So, let's agree that for the sake of this book: 

**Specification Statement** (or simply **Statement**, with a capital 'S')

:   will be used instead of the words "test" and "test method"

**Specification** (or simply **Spec**, also with a capital 'S')

:   will be used instead of the words "test suite" and "test list"

**False Statement**

:   will be used instead of "failing test"

**True Statement**

:   will be used instead of "passing test"

From time to time I'll refer back to the "traditional" terminology, because it is better established and because you may have already heard some other established terms and wonder how they should be understood in the context of thinking of tests as a specification.

## The differences between executable and "traditional" specifications

You may be familiar with requirements specifications or design specifications that are written in plain English or other spoken language. However, our Specifications differ from them in at least few ways. In particular, the kind of Specification that we create by writing tests:

1.  Is not *completely* written up-front like many of such "traditional" specs have been written (which doesn't mean it's written after the code is done - more on this in the next chapters).
2.  Is executable -- you can run it to see whether the code adheres to the specification or not. This lowers the risk of inaccuracies in the Specification and falling our of sync with the production code.
3.  Is written in source code rather than in spoken language -- which is both good, as the structure and formality of code leave less room for misunderstanding, and challenging, as great care must be taken to keep such specification readable.

[^opensourcebook]: besides, this book is open source, so if you don't like the terminology, you are free to create a fork and change it to your liking!
