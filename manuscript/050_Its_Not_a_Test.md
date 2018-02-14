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
* wykonanie - kiedy otrzymujemy informację zwrotną od testu, czy kod produkcyjny zachowuje się tak jak opisano w specyfikacji. Jeśli nie, test powinien zakończyć się niepowodzeniem, fiaskiem (failed).
*  czytanie - to miejsce, w którym wykorzystujemy już napisaną specyfikację, aby uzyskać wiedzę na temat tego, jak korzystać z kodu produkcyjnego.

Dlatego nazwa "test" to trochę za mało, by oddać co tutaj robimy. Moje odczucia są takie, że inna nazwa mogłaby być lepsza - stąd określenie *specyfikacja*.

Odkrycie, że testy pełnią rolę specyfikacji jest dość niedawne i nie ma jeszcze jednolitej terminologii. Niektórzy lubią nazywać proces używania testów jako specyfikacje. *Specyfikacja przez przykłady* (specification by example) żeby przekazać, że testy są przykładami, które pomagają określić i wyjaśnić rozwijaną funkcjonalność. Niektórzy używają terminu BDD (*Behavior-Driven Development*), aby podkreślić, że pisanie testów polega na analizowaniu i opisywaniu zachowań. Ponadto możesz napotkać różne nazwy dla niektórych elementów takiego podejścia, na przykład "test" może być określany jako "specyfikacja", "przykład", "opis zachowania", lub "opis zachowania", albo "fakt o systemie". Zresztą, widzieliśmy w rozdziale o narzędziach, że xUnit.NET Framework oznacza każdy test atrybutem `[Fact]`, sugerując, że stwierdzamy pojedynczy fakt o tworzonym kodzie. Przy okazji, xUnit.NET pozwala nam również na określenie teorii na temat naszego kodu, ale zostawmy ten temat na inny czas.

Wziąwszy pod uwagę różnorodność w terminologii, umówmy się tak: żeby być spójnym przez całą książkę, ustanowię konwencje nazewniczą, ale ostatecznie Tobie zostawiam prawo wyboru tego, jaka nazwa jest dla Ciebie najodpowiedniejsza. Powodem takiego podejścia do nazewnictwa jest pedagogika - nie próbuję stworzyć ruchu na rzecz lansowania określonych pojęć, nie chcę wynaleźć nowej metodyki, ani niczego podobnego - mam nadzieję, że konsekwnetne używanie w książce poniższej terminologii, pozwoli Ci spojrzeć na niektóre rzeczy inaczej. Zgadzamy się więc, że ze względu na tę książkę: 

Specyfikacja Wymagania (**Specification Statement**), lub po prostu Wymaganie (**Statement**), wielką literą 'W'

:   będzie używane zamiast słowa "test" i "metoda testowa" ("test method")

Specyfikacja (**Specification**) również wielką literą 'S'

:   będzie używana zamiast słów "zestaw testów" ("test suite") i lista testów ("test list")

Wymaganie niespełnione (**False Statement**)

:   będzie używane zamiast "niepowodzenie testu" ("failing test")

Wymaganie spełnione (**True Statement**)

:   będzie używane zamiast "test, który przeszedł" ("passing test")

Od czasu do czasu będę powracał do "tradycyjnej" terminologii, ponieważ jest lepiej utrwalona w środowisku IT i ponieważ słyszeliście już jakieś inne terminy, które zdążyły się zadomowić w świadomości programistów. Z pewnością zastanawiacie się, jak należy je rozumieć w kontekście myślenia o testach jako specyfikacji.

## Różnice między "wykonywalnymi" specyfikacjami i tymi "tradycyjnymi"

Użytkownik może być zaznajomiony ze specyfikacjami wymagań lub specyfikacjami projektowymi napisanymi prostym językiem angielskim lub innym językiem mówionym. Jednakże nasze specyfikacje różnią się od nich w kilku kwestiach. W szczególności specyfikacja, którą tworzymy pisząc testy:

1. Nie jest *całkowicie* narzucona nam z góry, tak jak wiele "tradycyjnych" specyfikacji (co nie znaczy, że jest pisana po stworzeniu kodu - więcej na ten temat w następnych rozdziałach).
2. Jest "wykonywalna" - można ją uruchomić, aby sprawdzić, czy kod jest zgodny ze specyfikacją, czy też nie. Zmniejsza to ryzyko wystąpienia nieścisłości w Specyfikacji i nieprzystawalności Specyfikacji do kodu produkcyjnego.
3. Jest napisana za pomocą kodu źródłowego, a nie w języku mówionym - co jest dobre, ponieważ struktura kodu i sformalizowany styl pozostawiają mniej miejsca na nieporozumienia. Jednakże, jest to także wyzwanie, ponieważ należy zachować szczególną ostrożność, by utrzymać czytelność takiej Specyfikacji.