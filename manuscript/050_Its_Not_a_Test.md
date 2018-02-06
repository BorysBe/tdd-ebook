# To nie (tylko) test

Czy rolą testu jest tylko "weryfikacja" lub "sprawdzenie", że oprogramowanie działa? Z pewnością jest to istotna część jego `runtime value`, tj. wartości, którą otrzymujemy kiedy uruchamiamy test. Jednakże, gdy ograniczymy naszą perspektywę jedynie do testów, możemy dojść do wniosku, że jedyną cenną rzeczą w przeprowadzaniu testu jest możliwość jego wykonania i wyświetlenia wyniku. Takie działania, jak projektowanie lub implementowanie testu, miałyby tylko wartość w stworzeniu czegoś, co można uruchomić. Czytelność testu miałaby wartość tylko podczas debugowania. Czy tak jest w istocie?

W tym rozdziale będę przekonywał, że czynności polegające na projektowaniu, implementowaniu, kompilowaniu i czytaniu testu są bardzo ważnymi działaniami. I pozwalają traktować testy jako coś więcej niż tylko "automatyczną kontrolę".

## Kiedy test staje się czymś więcej 

Studiowałem w Łodzi, dużym mieście w centrum Polski. Jak (zapewne) wszyscy inni studenci we wszystkich krajach świata, mieliśmy wykłady, ćwiczenia i egzaminy. Egzaminy były dość trudne. Ponieważ moja grupa informatyczna była na wydziale elektroniki i elektrotechniki, musieliśmy zrozumieć wiele przedmiotów, które nie miały nic wspólnego z programowaniem. Na przykład: elektrotechnika, fizyka ciała stałego lub metrologia elektryczna i elektroniczna.

Wiedząc, że egzaminy były trudne i że trudno było nauczyć się wszystkiego w trakcie semestru, wykładowcy czasami dostarczali nam przykładowe egzaminy z poprzednich lat. Pytania różniły się od tych na naszych egzaminach, ale struktura i rodzaje zadawanych pytań (praktyka i teoria itp.) były podobne. Zazwyczaj otrzymywaliśmy przykładowe pytania, zanim nauka stawała się naprawdę ciężka (co zwykle miało miejsce pod koniec semestru). Zgadnij, co się wtedy działo? Jak możecie podejrzewać, nie skorzystaliśmy z testów, które otrzymaliśmy tylko po to, by "zweryfikować" lub "sprawdzić" naszą wiedzę po ukończeniu nauki. Wręcz przeciwnie - zbadanie tych testów było pierwszym krokiem naszego przygotowania. Dlaczego tak było? Jakie było zastosowanie tych testów, skoro wiedzieliśmy, że i tak nie znaliśmy większości odpowiedzi?

Myślę, że moi wykładowcy nie zgodziliby się wcale ze mną, ale mam dość zabawne spostrzeżenie, że to co robiliśmy było podobne do "Lean Software Development". Lean to filozofia gdzie kładzie się nacisk na pozbywanie się tego, co niepotrzebne (unikanie marnotrawstwa). Każda funkcjonalność lub produkt, które nie są nikomu potrzebne, są uważane za stratę, marnotrawstwo. To dlatego, że jeśli coś nie jest potrzebne teraz to nie ma najmniejszego powodu by założyć, że kiedykolwiek będzie potrzebne. Cała taka funkcjonalność lub produkt nie dodaje żadnej wartości biznesowej. Nawet jeśli kiedykolwiek *będzie* potrzebne, to - bardzo prawdopodobne - że i tak będzie wymagać pracy, aby dopasować to do potrzeb klienta. W takim przypadku, praca - która została włożona w oryginalne rozwiązanie teraz wymagające adaptacji - jest marnotrawstwem. To kosztowało, ale nie przyniosło korzyści (nie mówię o takich rzeczach jak demo dla klienta, ale gotowy, dopracowany produkt lub funkcjonalność.

Aby wyeliminować marnotrawstwo, zazwyczaj staramy się dodawać funkcjonalności których się od nas żąda, zamiast "wpychać" funkcjonalności do produktu w nadziei, że pewnego dnia staną się one przydatne. Innymi słowy, każda funkcja ma zaspokoić konkretną potrzebę. Jeśli nie, pracę uważa się za zmarnowaną, a pieniądze poszły w błoto.

Going back to the exams example, why can the approach of first looking through the exemplary tests be considered "lean"? That's because, when we treat passing an exam as our goal, then everything that does not put us closer to this goal is considered wasteful. Let's suppose the exam concerns theory only -- why then practice the exercises? It would probably pay off a lot more to study the theoretical side of the topics. Such knowledge could be obtained from those exemplary tests. So, the tests were a kind of specification of what was needed to pass the exam. It allowed us to pull the value (i.e. our knowledge) from the demand (information obtained from realistic tests) rather that push it from the implementation (i.e. learning everything in a course book chapter after chapter).

So the tests became something more. They proved very valuable before the "implementation" (i.e. learning for the exam) because:

1.  they helped us focus on what was needed to reach our goal
2.  they brought our attention away from what was **not** needed to reach our goal

That was the value of a test before learning. Note that the tests we would usually receive were not exactly what we would encounter at the time of the exam, so we still had to guess. Yet, the role of a **test as a specification of a need** was already visible.

## Taking it to the software development land

I chose this lengthy metaphor to show you that a writing a "test" is really another way of specifying a requirement or a need and that it's not counter-intuitive to think about it this way -- it occurs in our everyday lives. This is also true in software development. Let's take the following "test" and see what kind of needs it specifies: 

```csharp
var reporting = new ReportingFeature();
var anyPowerUser = Any.Of(Users.Admin, Users.Auditor);
Assert.True(reporting.CanBePerformedBy(anyPowerUser));
```

(In this example, we used `Any.Of()` method that returns any enumeration value from the specified list. Here, we say "give me a value that is either `Users.Admin` or `Users.Auditor`".)

Let's look at those (only!) three lines of code and imagine that the production code that makes this "test" pass does not exist yet. What can we learn from these three lines about what this production code needs to supply? Count with me: 

1. We need a reporting feature.
2. We need to support a notion of users and privileges.
3. We need to support a concept of power user, who is either an administrator or an auditor.
4. Power users need to be allowed to use the reporting feature (note that it does not specify which other users should or should not be able to use this feature -- we would need a separate "test" for that).

Also, we are already after the phase of designing an API (because the test is already using it) that will fulfill the need. Don't you think this is already quite some information about the application functionality from just three lines of code?

## A Specification rather than a test suite

I hope you can see now that what we called "a test" can also be seen as a kind of specification. This is also the answer to the question I raised at the beginning of this chapter. 

In reality, the role of a test, if written before production code, can be broken down even further:

* designing a scenario - is when we specify our requiremnts by giving concrete examples of behaviors we expect
* writing the test code - is when we specify an API through which we want to use the code that we are testing
* compiling - is when we get feedback on whether the production code has the classes and methods required by the specification we wrote. If it doesn't, the compilation will fail. 
* execution - is where we get feedback on whether the production code exhibits the behaviors that the specification describes
* reading - is where we use the already written specification to obtain knowledge about the production code.
 
Thus, the name "test" seems like narrowing down what we are doing here too much. My feelings is that maybe a different name would be better - hence the term *specification*.

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
