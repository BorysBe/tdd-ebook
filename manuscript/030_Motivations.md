# Motywacja -- pierwszy krok w uczeniu się TDD

Piszę tę książkę, ponieważ jestem entuzjastą TDD. Wierzę, że TDD ma przewagę nad innymi sposobami wytwarzania oprogramowania, których używałem. 
Myślę, że wielu programistów podziela moje przekonania. To skłania mnie do zadania pytania - dlaczego więcej osób nie miałoby uczyć się TDD i dlaczego nie miałoby stosować TDD w swojej pracy? Wciąż nie mogę powiedzieć, że TDD jest głównym nurtem stosowanym w wytwarzaniu oprogramowania. Podczas mojej kariery zawodowej nie widziałem wystarczająco dużo przykładów, które by zdawały się potwierdzać taki stan rzeczy.

Drogi czytelniku! Już zdobyłeś mój szacunek, bo zdecydowałeś się sięgnąć po książkę, zamiast budować swoje rozumienie TDD na podstawie miejskich legend i swoich wyobrażeń. Nieważne, czy to Twoja pierwsza książka podejmująca temat TDD, czy też miałeś styczność z innymi - jestem zaszczycony i szczęśliwy, że dziś wybrałeś moją książkę. Mam nadzieję, że przeczytasz ją od deski do deski. Gdybyś jeszcze się wahał, chcę zadać Ci pomocnicze pytanie, które pomoże Ci stwierdzić, czy naprawdę masz ochotę na czytanie. Dlaczego właściwie chcesz się uczyć o TDD?

Kwestionując Twoją motywację, nie staram się zniechęcać Cię do czytania. Raczej 
chciałbym, byś dobrze wiedział co chcesz osiągnąć przeczytawszy moją książkę. Kilka lat temu uczyłem pewną osobę, która interesowała się TDD. Razem zaczęliśmy pracować nad małym projektem, by ta osoba mogła nabyć niezbędnych umiejętności poprzez praktykę, ja zaś siedziałem obok niej, udzielając wskazówek. Tych lekcji było trzy, lub cztery - potem mój uczeń zrezygnował mając "pilniejsze rzeczy do zrobienia", "nie mając czasu". Od tamtej pory, nie wykorzystywał więcej TDD w pracy ani nie starał się zrozumieć niczego więcej. Nawet dzisiaj, zastanawiam się, co było jego motywacją i dlaczego ta wyparowała?

Z biegiem czasu zauważyłem, że niektórzy z nas (wliczając w to mnie) mogą sądzić, że "niestety muszą" nauczyć się czegoś (nie "pragną" tego) z różnych powodów, np. żeby dostać awans, zdobyć certyfikat, dodać coś do CV, lub po prostu być na bieżąco z nowinkami. Niefortunnie, Test-Driven Development zdaje się należeć do kategorii "niestety muszę" dla wielu ludzi. Taki rodzaj motywacji będzie trudny do utrzymania na dłuższą metę.

Innym powodem dla którego można chcieć uczyć się TDD, są błędne oczekiwania.  
Niektórzy z nas mają mgliste wyobrażenie o rzeczywistych kosztach i zyskach, jakie daje TDD. Wiedząc, że jest cenione i chwalone przez innych, możemy wyciągnąć wnioski, że to będzie idealne rozwiązanie również dla nas.
Dla przykładu, ktoś oczekujący poprawnie działającego kodu mógł usłyszeć, że dzięki TDD "kod staje się bardziej przetestowany". Jeśli nie mamy wiedzy, dlaczego warto wprowadzać TDD do projektu, możemy uważać, iż należy pisać najpierw testy przed kodem, po to tylko, by zapewnić 100% pokrycia kodu. Nie zrozumcie mnie źle - to częściowo może być prawdą, aczkolwiek w takim podejściu gubimy istotę TDD. Co więcej, gdy od TDD oczekiwaliśmy czegoś, czego ono nie daje, możemy być bardzo rozczarowani. Słyszałem wiele osób, które twierdziły "nie potrzebuję TDD, bo potrzebne są mi testy systemowe dające większą pewność, co do poprawnego działania produktu" albo "po co mi testy jednostkowe[^notonlyunittests] kiedy już mam testy integracyjne, smoke-testy, sanity-testy, exploration-testy, etc...?". Wiele razy byłem świadkiem, że porzucano idee TDD zanim ją w ogóle zrozumiano.

Czy nauka TDD ma dla Ciebie priorytet? Czy jesteś zdeterminowany, by wypróbować TDD i naprawdę się tego nauczyć? Jeśli jest inaczej - hej - słyszałem, że nowy sezon Gry o Tron pojawił się w telewizji, czemu nie miałbyś zająć się właśnie nim? Dobra, tylko się przekomarzam, aczkolwiek mówi się, że zasady TDD są łatwe do zrozumienia, ale ciężko być prawdziwym ekspertem ("easy to learn, hard to master"[^easytolearn]). Dlatego, bez odrobiny odwagi, będzie ciężko. Szczególnie, że zamierzam wprowadzać Cię w temat powoli, stopniowo, byś otrzymał lepsze wyjaśnienie różnych technik i sposobów pisania.

Jakie jest TDD?
------------------

Razem z bratem lubiliśmy grać w gry video gdy byliśmy dziećmi -- szczególnie miło wspominam grę Tekken 3 -- japoński beat'em up na Sony Playstation (PSX). Ukończenie gry wszystkimi zawodnikami i odblokowani wszystkich ukrytych bonusów, mini-gier etc. zajmowało jeden dzień. Ktoś mógłby powiedzieć, że od tego momentu gra nie oferuje niczego więcej. Dlaczego więc, z bratem, graliśmy w nią ponad rok?

![Tekken3](images/Tekken3-gray.png)

To dlatego, że każdy wojownik w grze posiadał mnóstwo kombosów, kopnięć i uderzeń rękami, które można było łączyć na różne sposoby. Niektóre dało się zastosować tylko w określonych sytuacjach, inne mogłem użyć prawie zawsze bez ryzyka narażenia się na kontratakt. Mogłem zejść z lini ataku przeciwnika, a także byłem w stanie wykopać przeciwnika w powietrze, gdzie nie mógł już blokować moich ciosów i wykonać dodatkowy atak zanim upadł na ziemię. Ta powietrzna technika nazywa się "juggles". W tamtych czasach pojawiały się czasopisma, które każdego miesiąca publikowały listę nowo odkrytych "juggles", nie pozwalając - w ten spoób - zgasnąć fascynacji graczy przez ponad rok.

Tak, łatwo było się nauczyć grać w Tekken -- mogłem poświęcić zaledwie jedną godzinę trenując najważniejsze ruchy postaci i byłem w stanie już "używać" danego zawodnika, ale wiedziałem, że lepiej bym walczył gdybym zdobył doświadczenie i wiedzę, które techniki są ryzykowne, a które nie, których ataków używać w określonych sytuacjach, jak łączyć je ze sobą, jak zmniejszyć możliwość kontrataku. Nic dziwnego, że wkrótce pojawiło się wiele turniejów, gdzie gracze mogli walczyć o chwałę, sławę i nagrody. Nawet dziś, można obejrzeć niektóre z tych legendarnych pojedynków na YouTube.

TDD is like Tekken. You probably heard the mantra "red-green-refactor" or the general advice "write your test first, then the code", maybe you even did some experiments on your own where you were trying to implement a bubble-sort algorithm or other simple stuff by starting with a test. But that is all like practicing Tekken by trying out each move on its own on a dummy opponent, without the context of real-world issues that make the fight really challenging. And while I think such exercises are very useful (in fact, I do a lot of them), I find an immense benefit in understand the bigger picture of real-world TDD usage as well.

Some people I talk to about TDD sum up what I say to them as, "This is really demotivating -- there are so many things I have to watch out for, that it makes me never want to start!". Easy, don’t panic -- remember the first time you tried to ride a bike -- you might have been really far back then from knowing traffic regulations and following road signs, but that didn't really keep you away, did it?  

I find TDD very exciting and it makes me excited about writing code as well. Some guys of my age already think they know all about coding, are bored with it and cannot wait until they move to management or requirements or business analysis, but hey! I have a new set of techniques that makes my coding career challenging again! And it is a skill that I can apply to many different technologies and languages, making me a better developer overall! Isn't that something worth aiming for?

## Let's get it started!

In this chapter, I tried to provoke you to rethink your attitude and motivation. If you are still determined to learn TDD with me by reading this book, which I hope you are, then let's get to work! 

[^easytolearn]: I don’t know who said it first, I searched the web and found it in few places where none of the writers gave credit to anyone else for it, so I decided just to mention that I’m not the one that coined this phrase.

[^notonlyunittests]: By the way, TDD is not only about unit tests, which we will get to eventually.

